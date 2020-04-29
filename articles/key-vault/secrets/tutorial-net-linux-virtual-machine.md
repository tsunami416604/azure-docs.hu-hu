---
title: Oktatóanyag – a Linux rendszerű virtuális gépek és a ASP.NET-konzol alkalmazás használata a titkok tárolására Azure Key Vaultban | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egy ASP.NET Core alkalmazást a titkos kulcs Azure Key vaultból való beolvasásához.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 50810dd1fbf2d97989df47b537ef5c574be059d2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81423151"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux rendszerű virtuális gép és .NET-alkalmazás használata a titkok tárolására Azure Key Vault

Azure Key Vault segít az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges titkok, például az API-kulcsok és az adatbázis-kapcsolati karakterláncok védelemmel való ellátásában.

Ebben az oktatóanyagban egy .NET-konzolos alkalmazást állít be, amely az Azure-erőforrások felügyelt identitások használatával olvassa be a Azure Key Vault információit. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos kód tárolása Key Vault
> * Azure Linux rendszerű virtuális gép létrehozása
> * [Felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális géphez
> * Adja meg a szükséges engedélyeket a konzol alkalmazás számára az adatok olvasásához Key Vault
> * Titkos kód beolvasása Key Vault

Mielőtt továbblépne, olvassa el a [Key Vault alapfogalmait](../general/basic-concepts.md)ismertető témakört.

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .
* [Azure CLI 2,0 vagy újabb](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy Azure Cloud shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>A Managed Service Identity megismerése

A Azure Key Vault biztonságosan tárolhatók a hitelesítő adatok, így azok nem szerepelnek a kódban, de a lekéréshez a hitelesítéshez szükséges a Azure Key Vault. Key Vault hitelesítéséhez azonban szükség van egy hitelesítő adatra. Ez egy klasszikus rendszertöltő probléma. Az Azure és a Azure Active Directory (Azure AD) segítségével a Managed Service Identity (MSI) rendszerindító identitást biztosíthat, amely sokkal egyszerűbbé teszi a dolgok megkezdését.

Ha az MSI-t olyan Azure-szolgáltatáshoz engedélyezi, mint például a Virtual Machines, a App Service vagy a functions, az Azure létrehoz egy szolgáltatásnevet a szolgáltatás példányához Azure Active Directory. Az egyszerű szolgáltatásnév hitelesítő adatait a szolgáltatás egy példányához adja.

![MSI](../media/MSI.png)

Ezután a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást egy hozzáférési jogkivonat beszerzéséhez.
A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot a `az group create` parancs használatával. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az USA nyugati régiójában. Válassza ki az erőforráscsoport nevét, és cserélje le `YourResourceGroupName` a következő példában szereplőre:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ezt az erőforráscsoportot az oktatóanyag során használhatja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ezután hozzon létre egy Key vaultot az erőforráscsoporthoz. Adja meg az alábbi információkat:

* Key Vault neve: 3 – 24 karakterből álló karakterlánc, amely csak számokat, betűket és kötőjeleket tartalmazhat (0-9, a-z, A-Z és \- ).
* Erőforráscsoport neve
* Hely: **USA nyugati** régiója

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton csak az Azure-fiókja jogosult bármilyen művelet végrehajtására ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most adjon hozzá egy titkos kulcsot. Valós forgatókönyv esetén előfordulhat, hogy egy SQL-kapcsolódási sztringet vagy bármilyen más olyan információt tárol, amelyet biztonságosan meg kell őriznie, de elérhetővé kell tennie az alkalmazás számára.

Ebben az oktatóanyagban írja be a következő parancsokat a titkos kulcs létrehozásához a kulcstartóban. A titkos kód neve **AppSecret** , és az értéke **keresési kifejezésként**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

Hozzon létre egy virtuális `az vm create` gépet a paranccsal.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. Az `--generate-ssh-keys` általunk használt paraméter az SSH-kulcs automatikus létrehozásához és az alapértelmezett kulcs helyére (**~/.ssh**) való elhelyezésére szolgál. Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő példa kimenete azt mutatja, hogy a virtuális gép létrehozási művelete sikeres volt.

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

Jegyezze `publicIpAddress` fel a virtuális gép kimenetét. Ezt a lakcímet fogja használni a virtuális gép későbbi lépésekben való eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez

Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez a következő parancs futtatásával:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenetének a következőket kell tennie:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Jegyezze fel a `systemAssignedIdentity`-t. Ezt a következő lépésben használhatja.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép személyazonosságának engedélyezése Key Vault

Most Key Vault engedélyt adhat a létrehozott identitásnak. Futtassa az alábbi parancsot:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépre

Jelentkezzen be a virtuális gépre egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>A .NET Core telepítése Linux rendszeren

Linux rendszerű virtuális gépen:

A következő parancsok futtatásával regisztrálja megbízhatóként a Microsoft-termékkulcsot:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

A kívánt verziójú Host Package-hírcsatorna beállítása operációs rendszer alapján:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Telepítse a .NET-et, és keresse meg a verziót:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Minta .NET-alkalmazás létrehozása és futtatása

Futtassa az alábbi parancsokat. A ""Helló világ!"alkalmazás" szöveg jelenik meg a konzolon.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>A konzol alkalmazás szerkesztése a titkos kód beolvasásához

Nyissa meg a Program.cs fájlt, és adja hozzá a következő csomagokat:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Ez egy kétlépéses folyamat, amely lehetővé teszi, hogy az alkalmazás hozzáférjen a titkos kulcshoz a kulcstartóban.

1. A virtuális gép helyi MSI-végpontján lévő jogkivonat beolvasása, amely a tokent lekéri Azure Active Directoryról.
1. Adja át a tokent Key Vault és olvassa be a titkot.

   Szerkessze a Class (osztály) fájlt, hogy az tartalmazza a következő kódot:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

Most, hogy megismerte, hogyan végezheti el a műveleteket a Azure Key Vaultekkel egy Azure Linux rendszerű virtuális gépen futó .NET-alkalmazásban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége rájuk, törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez jelölje ki a virtuális gép erőforráscsoportját, és válassza a **Törlés** lehetőséget.

Törölje a Key vaultot a `az keyvault delete` paranccsal:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
