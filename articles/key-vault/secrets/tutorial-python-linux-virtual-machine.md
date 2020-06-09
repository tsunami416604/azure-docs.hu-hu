---
title: Oktatóanyag – a Linux rendszerű virtuális gépek és a Python-alkalmazások használata a titkok tárolására Azure Key Vaultban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy Python-alkalmazást a Azure Key Vault titkos kódjának beolvasásához.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: mbaldwin
ms.custom: mvc, tracking-python
ms.openlocfilehash: 6b3fb07322009134a75621a19cd013e2f967972a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/09/2020
ms.locfileid: "84561613"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux rendszerű virtuális gép és egy Python-alkalmazás használata a titkok tárolására Azure Key Vault

A Azure Key Vault segítségével megvédheti a titkokat, például az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges API-kulcsokat és adatbázis-kapcsolati karakterláncokat.

Ebben az oktatóanyagban egy Azure-webalkalmazást állít be, amely az Azure-erőforrások felügyelt identitások használatával olvassa be a Azure Key Vault információit. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos kulcs tárolása a kulcstartóban
> * Linuxos virtuális gép létrehozása
> * [Felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális géphez
> * Adja meg a konzol alkalmazás szükséges engedélyeit a Key Vault adatainak olvasásához
> * Titkos kód beolvasása a kulcstartóból

Mielőtt továbblépne, tájékozódjon a [Key Vault kapcsolatos alapfogalmakról](../general/basic-concepts.md).

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Az [Azure CLI verziója 2.0.4 vagy újabb](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy Azure Cloud shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>A Managed Service Identity megismerése

Azure Key Vault a hitelesítő adatokat biztonságosan tárolhatja, hogy ne legyenek a kódban. A lekéréséhez hitelesítenie kell Azure Key Vault. Key Vault hitelesítéséhez azonban szükség van egy hitelesítő adatra. Ez egy klasszikus rendszertöltő probléma. Az Azure és a Azure Active Directory (Azure AD) segítségével a Managed Service Identity (MSI) egy rendszerindítási identitást biztosít, amely egyszerűbbé teszi a dolgok megkezdését.

Ha az MSI-t olyan Azure-szolgáltatáshoz engedélyezi, mint például a Virtual Machines, a App Service vagy a functions, az Azure létrehoz egy szolgáltatásnevet a szolgáltatás példányához az Azure AD-ben. Az egyszerű szolgáltatásnév hitelesítő adatait a szolgáltatás egy példányához adja.

![MSI](../media/MSI.png)

Ezután a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást egy hozzáférési jogkivonat beszerzéséhez. A kód a helyi MSI-végponttól kapott hozzáférési jogkivonatot használja a Azure Key Vault szolgáltatásba való hitelesítéshez.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az `az group create` USA nyugati régiójában található parancs használatával a következő kóddal. Cserélje le a `YourResourceGroupName` nevet tetszőleges névre.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ezt az erőforráscsoportot az oktatóanyag során használhatja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ezután létre kell hoznia egy kulcstartót az előző lépésben létrehozott erőforráscsoporthoz. Adja meg az alábbi információkat:

* Key Vault neve: a névnek 3-24 karakterből álló karakterláncnak kell lennie, és csak 0-9, a-z, A-Z és kötőjeleket (-) tartalmazhat.
* Az erőforráscsoport neve.
* Hely: **USA nyugati régiója**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Előfordulhat, hogy egy SQL-alapú kapcsolódási sztringet vagy bármely más olyan információt szeretne tárolni, amelyet mind biztonságos, mind elérhetővé kell tenni az alkalmazás számára.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az *AppSecret* nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

Hozzon létre egy virtuális gépet a `az vm create` parancs használatával.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter automatikusan létrehoz egy SSH-kulcsot, és az alapértelmezett kulcs helyére helyezi (**~/.ssh**). Ha ehelyett egy adott kulcsot szeretne létrehozni, használja a `--ssh-key-value` kapcsolót.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő példa kimenete azt mutatja, hogy a virtuális gép létrehozása sikeres volt:

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

Jegyezze fel a saját virtuális gépe `publicIpAddress` kimenetét. Ezt a lakcímet fogja használni a virtuális gép későbbi lépésekben való eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez

Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez a következő parancs futtatásával:

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

Jegyezze fel a-t `systemAssignedIdentity` . Ezt a következő lépéssel használhatja.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép személyazonosságának engedélyezése Key Vault

Most Key Vault engedélyt adhat a létrehozott identitásnak. Futtassa az alábbi parancsot:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépre

Jelentkezzen be a virtuális gépre egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>A Python-könyvtár telepítése a virtuális gépre

Töltse le és telepítse [a Python-függvénytárat a http](https://pypi.org/project/requests/2.7.0/) Get-hívások létrehozásához.

## <a name="create-edit-and-run-the-sample-python-app"></a>A Python-alkalmazás létrehozása, szerkesztése és futtatása

Hozzon létre egy **sample.py**nevű Python-fájlt.

Nyissa meg a Sample.py, és szerkessze úgy, hogy az tartalmazza a következő kódot:

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

Az előző kód kétlépéses folyamatot hajt végre:

   1. A virtuális gép helyi MSI-végpontján lévő jogkivonat beolvasása. Ezután a végpont beolvassa a tokent Azure Active Directoryból.
   1. Átadja a tokent a kulcstartónak, és beolvassa a titkot.

Futtassa a következő parancsot. Ekkor meg kell jelennie a titkos értéknek.

```console
python Sample.py
```

Ebben az oktatóanyagban megtanulta, hogyan használhatja a Azure Key Vaultt egy Linux rendszerű virtuális gépen futó Python-alkalmazással.

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, és válassza a **Törlés** lehetőséget.

Törölje a Key vaultot a `az keyvault delete` paranccsal:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
