---
title: Oktatóanyag – használata Linux rendszerű virtuális gép és a egy ASP.NET-Konzolalkalmazás titkos kulcsok tárolására az Azure Key Vault |} A Microsoft Docs
description: Ebben az oktatóanyagban elsajátíthatja, hogyan konfigurálhatja az ASP.NET Core-alkalmazás az Azure Key vault titkos olvasásához.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 576ce0abc15a646738fea57dfabf43c889635a4b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416933"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Oktatóanyag: Linux rendszerű virtuális gép és a egy .NET-alkalmazás titkos kulcsok tárolására az Azure Key Vault használatával

Az Azure Key Vault segít, például az API-kulcsokat és az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati karakterláncok titkainak védelmére.

Ebben az oktatóanyagban beállította egy .NET-konzolalkalmazást az Azure Key Vault származó információk olvasásához az Azure-erőforrások felügyelt identitások használatával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * A Key Vaultban titkos Store
> * Egy Azure-beli Linuxos virtuális gép létrehozása
> * Engedélyezze a [identitás](../active-directory/managed-identities-azure-resources/overview.md) a virtuális gép
> * A szükséges a Konzolalkalmazás adatokat olvasni a Key Vault-engedélyek megadására
> * A Key Vault titkos kulcs lekérése

Mielőtt továbbmennénk, olvassa el [key vault alapvető fogalmait](key-vault-whatis.md#basic-concepts).

## <a name="prerequisites"></a>Előfeltételek

* [Git](https://git-scm.com/downloads).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
* [Az Azure CLI 2.0-s vagy újabb](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) vagy az Azure Cloud Shellben.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Felügyeltszolgáltatás-identitás ismertetése

Az Azure Key Vault biztonságosan tárolja a hitelesítő adatokat, így azok nem találhatók meg a kódban, azonban a lekérésükhöz hitelesítés szükséges az Azure Key Vaultban. Azonban hitelesítést a Key Vault, kell hitelesítő adatokat. Klasszikus rendszerindítási hiba. Az Azure és az Azure Active Directory (Azure AD) révén a Felügyeltszolgáltatás-identitás (MSI) biztosíthat egy rendszer-indításkori identitás, amely megkönnyíti a jóval egyszerűbb, felhatalmazást.

MSI engedélyezésekor például a virtuális gépek, az App Service-ben vagy a függvények az Azure-szolgáltatások az Azure létrehoz egy egyszerű szolgáltatást a service-példány az Azure Active Directoryban. A hitelesítő adatokat, kódtárba a szolgáltatásnévhez tartozó, a szolgáltatás példánya.

![MSI](media/MSI.png)

Ezután a kód meghívja a egy helyi metadata szolgáltatás érhető el a az Azure-erőforráshoz hozzáférési jogkivonatot kapjon.
A kód a helyi MSI_ENDPOINT-tól beszerzett hozzáférési jogkivonattal végzi el a hitelesítést az Azure Key Vault szolgáltatásban.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure-bA az Azure CLI-vel, írja be:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot a használatával a `az group create` parancsot. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Hozzon létre egy erőforráscsoportot az USA nyugati régiójában. Adjon meg egy nevet az erőforráscsoport, és cserélje le `YourResourceGroupName` az alábbi példában:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Ez az oktatóanyag során az erőforráscsoport használja.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Következő lépésként hozzon létre egy kulcstartót az erőforráscsoportban. Adja meg az alábbi információkat:

* A Key vault neve: egy karakterlánc, amely csak számokat, betűket és kötőjeleket tartalmazhat 3 – 24 karakter (0-9, a – z, A-Z, és \- ).
* Erőforráscsoport neve
* Hely: **USA nyugati régiója**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

Ezen a ponton csak az Azure-fiók jogosult végez semmilyen olyan műveletet az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Ezután adjon hozzá egy titkos kulcsot. A való életből vett helyzet akkor is tárolhatnak, egy SQL-kapcsolati karakterlánc vagy bármely más biztonságos megtartani, de az alkalmazás számára elérhetővé tenni kívánt információkat.

A jelen oktatóanyag esetében írja be a következő parancsok futtatásával hozzon létre egy titkos kulcsot a kulcstartóban. A titkos kulcs nevezzük **AppSecret** , értéke pedig **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Linuxos virtuális gép létrehozása

A virtuális gép létrehozása a `az vm create` parancsot.

A következő példa létrehoz egy **myVM** nevű virtuális gépet, és hozzáad egy **azureuser** nevű felhasználói fiókot. A `--generate-ssh-keys` paraméter USA segítségével automatikusan létrehozhat egy SSH-kulcsának, és tegye a kulcsok alapértelmezett helyén ( **~/.ssh**). Ha konkrét kulcsokat szeretne használni, használja az `--ssh-key-value` paramétert.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

A virtuális gép és a kapcsolódó erőforrások létrehozása csak néhány percet vesz igénybe. Az alábbi példa kimenet látható, hogy a virtuális gép létrehozási művelet sikeresen befejeződött.

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

Jegyezze fel a `publicIpAddress` a kimenetben a virtuális gépről. Ezt a címet fogja használni a későbbi lépésekben a virtuális gép eléréséhez.

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális gép

A virtuális gépet a rendszer által hozzárendelt identitás létrehozása a következő parancs futtatásával:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

A parancs kimenete kell lennie:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Jegyezze fel a `systemAssignedIdentity`. Használhatja a következő lépésben.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>A virtuális gép identitása engedélyt ad a Key Vault

Most már engedélyt adhat a Key Vault létrehozott identitásra. Futtassa a következő parancsot:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Jelentkezzen be a virtuális Gépre

Jelentkezzen be a virtuális gép egy terminál használatával.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>A .NET Core telepítése Linux rendszeren

A Linux rendszerű virtuális gépen:

Regisztráljon a Microsoft-termékkulcs megbízhatóként a következő parancsok futtatásával:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Állítsa be a kívánt verziót gazdagép csomag hírcsatorna alapján operációs rendszer:

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

Telepítse a .NET és a verzió ellenőrzéséhez:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Létrehozása és futtatása a .NET-mintaalkalmazást

Futtassa a következő parancsokat. Megjelenik a "Hello World" nyomtatott a konzolhoz.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>A Konzolalkalmazás beolvasni a titkos kód szerkesztése

Nyissa meg a Program.cs fájlt, és adja hozzá ezeket a csomagokat:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Egy kétlépéses folyamat engedélyezése az alkalmazásnak a titkos kulcsot a kulcstartóban a osztály fájl módosítása.

1. A virtuális gépen, amely ezután lekéri egy tokent az Azure Active Directoryból olvassa be a jogkivonatot a helyi MSI-végpontról.
1. A jogkivonat átadni a Key Vault és a titkos kód beolvasása.

   Szerkessze a osztály fájlt a következő kódot tartalmazza:

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

Most már bemutattuk, hogyan lehet egy Azure-beli Linuxos virtuális gépen futó .NET-alkalmazásokban az Azure Key Vault műveletek végrehajtásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége, törölje az erőforráscsoportot, virtuális gép és minden kapcsolódó erőforrás. Ehhez válassza ki az erőforráscsoportot a virtuális Gépet, majd válassza a **törlése**.

Törölje a key vault használatával a `az keyvault delete` parancsot:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Key Vault REST API-val](https://docs.microsoft.com/rest/api/keyvault/)
