---
title: Oktatóanyag – Linux os virtuális gép és ASP.NET konzolalkalmazás használata titkok tárolására az Azure Key Vaultban | Microsoft dokumentumok
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhat egy ASP.NET Core-alkalmazást az Azure Key Vault titkos adatainak olvasásához.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423151"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux os virtuális gép és .NET-alkalmazás használata titkos kulcsok tárolására az Azure Key Vaultban

Az Azure Key Vault segítségével megvédheti a titkos kulcsokat, például az API-kulcsokat és az adatbázis-kapcsolati karakterláncokat, amelyek az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükségesek.

Ebben az oktatóanyagban egy .NET konzolalkalmazást állít be az Azure Key Vaultból származó információk olvasásához az Azure-erőforrások felügyelt identitások használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos kulcs tárolása a Key Vaultban
> * Azure Linux os virtuális gép létrehozása
> * Felügyelt [identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális gépszámára
> * Adja meg a szükséges engedélyeket a konzolalkalmazás számára a Key Vaultból történő adatok olvasásához
> * Titkos kulcs lekérése a Key Vaultból

Mielőtt tovább mennénk, olvassa el a [kulcstartó alapvető fogalmait.](../general/basic-concepts.md)

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
* [Azure CLI 2.0 vagy újabb](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy Az Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>A felügyelt szolgáltatás identitásának megismerése

Az Azure Key Vault biztonságosan tárolhatja a hitelesítő adatokat, így azok nem a kódban, de a beolvasásukhoz hitelesíteni kell őket az Azure Key Vaultban. Azonban a Key Vault hitelesítéséhez, szüksége van egy hitelesítő adatokat. Ez egy klasszikus bootstrap probléma. Az Azure és az Azure Active Directory (Azure AD) segítségével a felügyelt szolgáltatásidentitás (MSI) olyan rendszerindítási identitást biztosít, amely sokkal egyszerűbbé teszi a dolgok megkezdését.

Ha engedélyezi az MSI-t egy Azure-szolgáltatáshoz, például a virtuális gépekhez, az App Service-hez vagy a Functionshez, az Azure létrehoz egy egyszerű szolgáltatást a szolgáltatás azure-beli Active Directoryban. A szolgáltatásnév hitelesítő adatait adja a szolgáltatás példányába.

![MSI](../media/MSI.png)

Ezután a kód meghívja az Azure-erőforráson elérhető helyi metaadat-szolgáltatást egy hozzáférési jogkivonat lekéréséhez.
A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure CLI-be, írja be a következőt:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy `az group create` erőforráscsoportot a paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az USA nyugati részén. Válasszon nevet az erőforráscsoportnak, és cserélje le `YourResourceGroupName` a következő példában:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ezt az erőforráscsoportot használja az oktatóanyagban.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ezután hozzon létre egy kulcstartót az erőforráscsoportban. Adja meg az alábbi információkat:

* Kulcstartó neve: 3–24 karakterből álló karakterlánc, amely csak számokat, betűket és kötőjeleket tartalmazhat ( \- 0-9, a-z, A-Z és .
* Erőforráscsoport neve
* Helyszín: **USA nyugati telephelye**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton csak az Azure-fiók jogosult bármilyen művelet végrehajtására az új tároló.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Most, hogy adjunk hozzá egy titkot. Valós forgatókönyv esetén előfordulhat, hogy egy SQL-kapcsolati karakterláncot vagy bármely más olyan információt tárol, amelyet biztonságosan kell megőriznie, de elérhetővé kell tennie az alkalmazás számára.

Ebben az oktatóanyagban írja be a következő parancsokat egy titkos kulcs létrehozásához a key vaultban. A titok neve **AppSecret** és értéke **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

Hozzon létre egy `az vm create` virtuális gép a parancsot.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. Az `--generate-ssh-keys` a paraméter, amelyet az SSH kulcs automatikus generálására és alapértelmezett kulcshelyre (**~/.ssh**) való elhelyezésére használtunk. Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. A következő példa kimenetazt mutatja, hogy a virtuális gép létrehozása művelet sikeres volt.

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

Jegyezze fel `publicIpAddress` a kimeneti a virtuális gép. Ezt a címet fogja használni a virtuális gép későbbi lépésekben való eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez

Hozzon létre egy rendszeráltal hozzárendelt identitást a virtuális géphez a következő parancs futtatásával:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenetének a következőnek kell lennie:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Jegyezze fel `systemAssignedIdentity`a . Használja a következő lépésben.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép identitásának engedélyezése a Key Vaultnak

Most már engedélyt adhat a Key Vaultnak a létrehozott identitáshoz. Futtassa az alábbi parancsot:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépbe

Jelentkezzen be a virtuális gépre egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>A .NET Core telepítése Linuxra

A Linux virtuális gépen:

Regisztrálja a Microsoft termékkulcsát megbízhatóként a következő parancsok futtatásával:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Állítsa be a kívánt verziógazdacsomag-hírcsatornát az operációs rendszer alapján:

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

Telepítse a .NET programot, és ellenőrizze a verziót:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Minta .NET alkalmazás létrehozása és futtatása

Futtassa az alábbi parancsokat. A konzolra kinyomtatva a "Hello World" feliratnak kell lennie.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>A konzolalkalmazás szerkesztése a titkos adatkéréshez

Nyissa meg Program.cs fájlt, és adja hozzá a csomagokat:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Ez egy kétlépéses folyamat az osztályfájl módosításához, hogy az alkalmazás hozzáférhessen a kulcstartó titkos kulcsához.

1. Token beolvasása a virtuális gép helyi MSI-végpontjáról, amely viszont lekéri a jogkivonatot az Azure Active Directoryból.
1. Adja át a tokent a Key Vaultnak, és olvassa el a titkos kulcsot.

   Az osztályfájl szerkesztése a következő kódot tartalmazza:

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

Most már megtanulta, hogyan hajthatja végre a műveleteket az Azure Key Vault egy Azure Linux-alapú virtuális gépen futó .NET-alkalmazásban.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást, ha már nincs rájuk szüksége. Ehhez jelölje ki a virtuális gép erőforráscsoportját, és válassza a **Törlés** lehetőséget.

Törölje a kulcstartót `az keyvault delete` a következő paranccsal:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](https://docs.microsoft.com/rest/api/keyvault/)
