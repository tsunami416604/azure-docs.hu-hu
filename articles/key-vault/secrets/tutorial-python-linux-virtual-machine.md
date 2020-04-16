---
title: Oktatóanyag – Linux os virtuális gép és Python-alkalmazás használata titkos kulcsok tárolására az Azure Key Vaultban | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhat egy Python-alkalmazást az Azure Key Vault titkos adatainak olvasására.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: df089f0338a177c08f4d9e88d55b501fd12f88f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423318"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux os virtuális gép és Python-alkalmazás használata titkok tárolására az Azure Key Vaultban

Az Azure Key Vault segítségével megvédheti a titkos kulcsokat, például az API-kulcsokat és az adatbázis-kapcsolati karakterláncokat, amelyek az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükségesek.

Ebben az oktatóanyagban egy Azure-webalkalmazást állít be az Azure Key Vaultból származó információk olvasásához az Azure-erőforrások felügyelt identitások használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos kulcs tárolása a kulcstartóban
> * Linuxos virtuális gép létrehozása
> * Felügyelt [identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális gépszámára
> * Adja meg a szükséges engedélyeket a konzolalkalmazás számára az adatok key vaultból történő olvasásához
> * Titok lekérése a kulcstartóból

Mielőtt tovább menne, győződjön meg róla, hogy ismeri a [Key Vault alapvető fogalmait.](../general/basic-concepts.md)

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* [Az Azure CLI 2.0.4-es vagy újabb verziója](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy az Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>A felügyelt szolgáltatás identitásának megismerése

Az Azure Key Vault biztonságosan tárolhatja a hitelesítő adatokat, így azok nem a kódban. A beolvasásukhoz hitelesítenie kell magát az Azure Key Vaultban. Azonban a Key Vault hitelesítéséhez, szüksége van egy hitelesítő adatokat. Ez egy klasszikus bootstrap probléma. Az Azure-on és az Azure Active Directoryn (Azure AD) keresztül a felügyelt szolgáltatásidentitás (MSI) egy rendszerindítási identitást biztosít, amely egyszerűbbé teszi a dolgok megkezdését.

Ha engedélyezi az MSI-t egy Azure-szolgáltatáshoz, például a virtuális gépekhez, az App Service-hez vagy a Functionshez, az Azure létrehoz egy egyszerű szolgáltatást a szolgáltatás azure AD-ben. A szolgáltatásnév hitelesítő adatait adja a szolgáltatás példányába.

![MSI](../media/MSI.png)

Ezután a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást egy hozzáférési jogkivonat lekéréséhez. A kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja az Azure Key Vault-szolgáltatás hitelesítéséhez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure CLI-be, írja be a következőt:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy `az group create` erőforráscsoportot az USA nyugati részén található parancs használatával a következő kóddal. Cserélje `YourResourceGroupName` le az Ön által választott nevet.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ezt az erőforráscsoportot használja az oktatóanyagban.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ezután hozzon létre egy key vault az erőforráscsoportban, amely az előző lépésben létrehozott. Adja meg az alábbi információkat:

* Key vault neve: A névnek 3-24 karakterből álló karakterláncnak kell lennie, és csak 0-9, a-z, A-Z és kötőjeleket (-) tartalmazhat.
* Az erőforráscsoport neve.
* Hely: **USA nyugati régiója**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Előfordulhat, hogy egy SQL-kapcsolati karakterláncot vagy bármely más olyan információt szeretne tárolni, amelyet biztonságosnak és az alkalmazás számára elérhetőnek kell tartani.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az *AppSecret* nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

Hozzon létre egy `az vm create` virtuális gép a parancs segítségével.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter automatikusan létrehoz egy SSH kulcsot, és az alapértelmezett kulcshelyre (**~/.ssh )** helyezi. Ha ehelyett egy adott kulcskészletet `--ssh-key-value` szeretne létrehozni, használja ezt a lehetőséget.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő példa kimenetazt mutatja, hogy a virtuális gép létrehozása sikeres volt:

```output
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

Jegyezze fel a `publicIpAddress` saját a kimeneti a virtuális gép. Ezt a címet fogja használni a virtuális gép későbbi lépésekben való eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez

Hozzon létre egy rendszeráltal hozzárendelt identitást a virtuális géphez a következő parancs futtatásával:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenete a következő.

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Jegyezze fel `systemAssignedIdentity`a . Használja a következő lépést.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép identitásának engedélyezése a Key Vaultnak

Most már engedélyt adhat a Key Vaultnak a létrehozott identitáshoz. Futtassa az alábbi parancsot:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépbe

Jelentkezzen be a virtuális gépre egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Python-könyvtár telepítése a virtuális gépre

Töltse le és telepítse a [kérelmeket](https://pypi.org/project/requests/2.7.0/) Python könyvtár, hogy HTTP GET hívásokat.

## <a name="create-edit-and-run-the-sample-python-app"></a>A minta Python-alkalmazás létrehozása, szerkesztése és futtatása

Hozzon létre egy **Sample.py**nevű Python-fájlt.

Nyissa meg Sample.py, és a következő kódot tartalmazza:

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

Az előző kód kétlépésben hajt végre:

   1. Lekéri a jogkivonatot a helyi MSI-végpont a virtuális gép. A végpont ezután lekéri a jogkivonatot az Azure Active Directoryból.
   1. Átadja a tokent a kulcstárolónak, és beolvassa a titkos kulcsot.

Futtassa a következő parancsot. Látnod kellene a titkos értéket.

```console
python Sample.py
```

Ebben az oktatóanyagban megtanulta, hogyan használhatja az Azure Key Vaultot egy Linux-virtuális gépen futó Python-alkalmazással.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást, ha már nincs rájuk szüksége. Ehhez jelölje ki a virtuális gép erőforráscsoportját, és válassza a **Törlés** lehetőséget.

Törölje a kulcstartót `az keyvault delete` a következő paranccsal:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
