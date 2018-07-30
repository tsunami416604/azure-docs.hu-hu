---
title: Rövid Azure-útmutató – Azure-webalkalmazások konfigurálása a Key Vault titkos kulcsainak olvasásához | Microsoft Docs
description: A rövid útmutató bemutatja, hogyan konfigurálhat ASP.NET Core-alkalmazást a Key Vault titkos kulcsainak olvasásához
services: key-vault
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 07a7bc5713f093e34a775aacab27094780ac6c7e
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39247825"
---
# <a name="quickstart-set-and-read-a-secret-from-key-vault-in-a-net-web-app"></a>Rövid útmutató: A Key Vault titkos kulcsainak beállítása és olvasása egy .NET-webalkalmazásban

Ebben a rövid útmutatóban azokat a lépéseket ismerheti meg, amelyekkel Azure-webalkalmazásokat konfigurálhat a Key Vaultban tárolt adatok felügyeltszolgáltatás-identitások használatával való olvasására. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Key Vault létrehozása.
> * Titkos kulcs tárolása a Key Vaultban.
> * Titkos kulcs lekérése a Key Vaultból.
> * Azure-webalkalmazás létrehozása.
> * [Felügyeltszolgáltatás-identitások engedélyezése](../active-directory/managed-service-identity/overview.md).
> * A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a Key Vaultból való olvasásához.

A folytatás előtt olvassa el az [alapvető fogalmakat](key-vault-whatis.md#basic-concepts), különös tekintettel a [felügyeltszolgáltatás-identitásról](../active-directory/managed-service-identity/overview.md) szóló részre

## <a name="prerequisites"></a>Előfeltételek

* Windows rendszeren:
  * A [Visual Studio 2017 szoftver 15.7.3-as vagy újabb verziója](https://www.microsoft.com/net/download/windows) a következő számítási feladatokkal:
    * ASP.NET és webfejlesztés
    * .NET Core platformfüggetlen fejlesztés
  * [.NET Core 2.1 SDK vagy újabb](https://www.microsoft.com/net/download/windows)

* Mac gépen:
  * https://visualstudio.microsoft.com/vs/mac/

* Összes platform:
  * A GIT [innen](https://git-scm.com/downloads) tölthető le.
  * Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.
  * [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) Az Azure CLI 2.0.4-es vagy újabb verziójára lesz szükség. Elérhető Windows, Mac és Linux rendszerekhez

## <a name="login-to-azure"></a>Bejelentkezés az Azure-ba

   Ha szeretne bejelentkezni az Azure-ba a parancssori felület használatával, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az_group_create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példában létrehozunk egy *<YourResourceGroupName>* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

A cikk az imént létrehozott erőforráscsoportot használja.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A következő lépésben létre fog hozni egy Key Vaultot az előző lépésben létrehozott erőforráscsoportban. Adja meg az alábbi információkat:

* Tároló neve: **Itt válasszon kulcstárolónevet**. A kulcstároló neve 3–24 karakter hosszúságú sztring lehet, és csak a következőket tartalmazhatja: 0–9, a–z, A–Z, és -.
* Erőforráscsoport neve: **Itt válasszon erőforráscsoport-nevet**.
* Hely: **USA keleti régiója**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Az Azure-fiókja jelenleg az egyetlen, amelyik jogosult arra, hogy műveleteket végezzen ezen az új tárolón.

## <a name="add-a-secret-to-key-vault"></a>Titkos kulcs hozzáadása a Key Vaulthoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat egy SQL-kapcsolati sztringet, vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, de elérhetővé kell tenni az alkalmazás számára. Ebben az oktatóanyagban a jelszó neve **AppSecret** lesz, és a **MySecret** értékét fogja tárolni.

Írja be az alábbi parancsokat, ha a **MySecret** értékét tároló titkos kulcsot szeretne létrehozni a Key Vaultban **AppSecret** névvel:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések elvégzése után rendelkeznie kell egy Azure Key Vaultban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="clone-the-repo"></a>Az adattár klónozása

A forrás szerkesztéséhez szükséges helyi másolat létrehozásához klónozza az adattárat a következő parancs futtatásával:

```
git clone https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart.git
```

## <a name="open-and-edit-the-solution"></a>Nyissa meg és szerkessze a megoldást

Szerkessze a program.cs-fájlt a mintának az adott kulcstároló nevével történő futtatásához.

1. Lépjen a key-vault-dotnet-core-quickstart mappához
2. Nyissa meg a key-vault-dotnet-core-quickstart.sln fájlt a Visual Studio 2017-ben
3. Nyissa meg a Program.cs-fájlt, és a korábban létrehozott kulcstároló nevére frissítse a <YourKeyVaultName> helyőrzőt.

Ez a megoldás [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) és [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-kódtárakat használ

## <a name="run-the-app"></a>Az alkalmazás futtatása

A Visual Studio 2017 főmenüjében válassza a Debug > Start without Debugging (Hibakeresés > Indítás hibakeresés nélkül) elemet. Amikor megjelenik a böngésző, lépjen a Névjegy oldalra. Megjelenik az AppSecret értéke.

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Ezt az alkalmazást az Azure-ban tesszük közzé, hogy megtekinthető legyen élő webalkalmazásként, illetve hogy a titkos érték lekérése is látható legyen

1. A Visual Studióban válassza a **key-vault-dotnet-core-quickstart** projektet.
2. Kattintson a **Publish** (Közzététel), majd a **Start** (Indítás) gombra.
3. Hozzon létre egy új **App Service-t**, majd kattintson a **Publish** (Közzététel) gombra.
4. Módosítsa az alkalmazás nevét a következőre: „keyvaultdotnetcorequickstart”
5. Kattintson a **Létrehozás** gombra.

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

## <a name="enable-managed-service-identities-msi"></a>Felügyeltszolgáltatás-identitások (MSI) engedélyezése

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát az Azure Key Vaultban az adatok lekéréséhez. A felügyeltszolgáltatás-identitás (MSI) segít leegyszerűsíteni ezt, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

1. Térjen vissza az Azure CLI-hez.
2. Futtassa az identitás hozzárendelésére szolgáló parancsot az alkalmazás identitásának létrehozásához:

```azurecli
az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
```

>[!NOTE]
>Ez a parancs egyenértékű azzal, mintha megnyitná a portált, és a webalkalmazás tulajdonságai között átállítaná a **Felügyeltszolgáltatás-identitás** beállítást **Be** értékűre.

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Engedélyek kiosztása az alkalmazásnak a Key Vault titkos kulcsainak olvasásához

Jegyezze fel a kimenetből származó adatokat, amikor [az Azure-ban közzéteszi az alkalmazást][]. Az adatok a következő formátumban jelennek meg:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ezután futtassa ezt a parancsot a kulcstárolója nevének és a fentről másolt PrincipalId értékének használatával:

```azurecli

az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get

```

## <a name="next-steps"></a>További lépések

* [Az Azure Key Vault kezdőlapja](https://azure.microsoft.com/services/key-vault/)
* [Az Azure Key Vault dokumentációja](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK for .NET](https://github.com/Azure/azure-sdk-for-net)
* [Azure REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/)
