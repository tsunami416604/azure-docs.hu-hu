---
title: Rövid útmutató – beállítása és lekérése egy Node-webalkalmazást az Azure Key vault titkos kulcs |} A Microsoft Docs
description: Ezt a gyorsútmutatót követve állítsa be, és egy Node-webalkalmazást az Azure Key Vault titkos kulcs lekérése
services: key-vault
documentationcenter: ''
author: prashanthyv
manager: sumedhb
ms.service: key-vault
ms.workload: identity
ms.topic: quickstart
ms.date: 09/05/2018
ms.author: barclayn
ms.custom: mvc
ms.openlocfilehash: 1e234b599325da0626c83a57d86ff977b88b5577
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56991274"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Gyors útmutató: Állítsa be, és a egy Node-webalkalmazást az Azure Key Vault titkos kulcs lekérése 

Ez a rövid útmutató bemutatja, titkos kulcs tárolása az Azure Key Vaultban, és hogyan kérheti le azt a webes alkalmazás használatával. A Key vault segítségével biztosíthatja az adatok biztonságos. A titkos érték megtekintéséhez kellene ebben a rövid útmutatóban az Azure-ban való futtatásához. A rövid útmutató Node.js kódot és Azure-erőforrásokhoz felügyelt-identitásokat alkalmaz. Az alábbiak végrehajtásának módját ismerheti meg:

* Kulcstartó létrehozása.
* Titkos kulcs tárolása a kulcstartóban.
* Titkos kulcs lekérése a kulcstartóból.
* Azure-webalkalmazás létrehozása.
* [Felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) engedélyezése a webalkalmazáshoz.
* A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a kulcstartóból való olvasásához.

Folytatás előtt győződjön meg arról, hogy ismeri a [alapvető fogalmait a Key vault](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> A Key Vault egy központi adattár a titkos kulcsok programozott módon való tárolásához. A használatához azonban az alkalmazásoknak és a felhasználóknak először hitelesíteniük kell magukat a Key Vaultban, azaz be kell mutatniuk egy titkos kulcsot. Ajánlott biztonsági eljárások megfelelnek az első titkos kulcsnak kell rendszeres időközönként kell-e forgatni. 
>
> A [felügyelt az Azure-erőforrásokat szolgáltatásidentitások](../active-directory/managed-identities-azure-resources/overview.md), az Azure-ban futó alkalmazások lekérése az identitás, amely az Azure automatikusan kezeli. Ez segít megoldani a *titkos kulcsok bemutatásának problémáját*, így a felhasználók és az alkalmazások az ajánlott eljárásokat követhetik, és nem kell aggódniuk az első titkos kulcs leváltása miatt.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4-es vagy újabb. Ez a rövid útmutatóhoz az Azure CLI helyi futtatása. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példában létrehozunk egy erőforráscsoportot az USA keleti régiójában.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

A cikkben végig az imént létrehozott erőforráscsoportot használjuk majd.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A Tovább gombra az erőforráscsoport, amely az előző lépésben létrehozott használatával hozzon létre egy kulcstartót. Bár ez a cikk "ContosoKeyVault" nevet, adjon meg egy egyedi nevet kell. Adja meg az alábbi információkat:

* A Key vault neve.
* Az erőforráscsoport neve. A név 3 – 24 karakter karakterláncnak kell lennie, és csak a 0-9, tartalmaznia kell a – z A – Z és kötőjelet (-).
* Hely: **USA keleti Régiójában**.

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

Jelenleg az Ön Azure-fiókja az egyetlen fiók, amelyik jogosult műveleteket végrehajtani ezen az új tárolón.

## <a name="add-a-secret-to-the-key-vault"></a>Titkos kulcs hozzáadása a kulcstartóhoz

Egy titkos kulcs hozzáadásával mutatjuk be ennek működését. Tárolhat SQL-kapcsolati sztringeket vagy bármely olyan adatot, amelyet biztonságosan kell tárolni, azonban az alkalmazás számára elérhetővé kell tenni. Ebben az oktatóanyagban a jelszó neve **AppSecret** lesz, és a **MySecret** értékét fogja tárolni.

Írja be az alábbi parancsokat, amelyek egy titkos kulcsot hoznak létre az **AppSecret** nevű kulcstartóban. A titkos kulcs értéke **MySecret** lesz.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

A titkos kódban tárolt érték megtekintése egyszerű szövegként:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

Ez a parancs megjeleníti a titkos információkat, beleértve az URI-t is. A fenti lépések végrehajtása után rendelkeznie kell egy, a kulcstartóban tárolt titkos kulcshoz tartozó URI-val. Jegyezze fel ezt az információt. Egy későbbi lépésben szüksége lesz rá.

## <a name="clone-the-repo"></a>Az adattár klónozása

A forrás szerkesztéséhez szükséges helyi másolat létrehozásához klónozza az adattárat. Futtassa az alábbi parancsot:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Függőségek telepítése

Futtassa a függőségek telepítéséhez a következő parancsokat:

```
cd key-vault-node-quickstart
npm install
```

A projekt által használt két csomópontmodulok: [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) és [azure-keyvault](https://www.npmjs.com/package/azure-keyvault).

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Hozzon létre egy [Azure App Service](https://azure.microsoft.com/services/app-service/) tervet. Ebben a csomagban több webalkalmazást is tárolhat.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Ezután hozzon létre egy webalkalmazást. A következő példában cserélje le a `<app_name>` egy globálisan egyedi névre (érvényes karakterek: a-z, 0-9, és -). A futtatókörnyezet beállítása: NODE|6.9. Összes támogatott futtatókörnyezet megtekintéséhez futtassa `az webapp list-runtimes`.

    ```
    # Bash
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```
A webalkalmazás létrehozása után az Azure CLI az alábbi példához hasonló eredményeket jelenít meg:

    ```
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
Keresse meg az újonnan létrehozott webalkalmazáshoz, és megtekintheti, hogy működik-e. Cserélje le `<app_name>` egy egyedi névre.

    ```
    http://<app name>.azurewebsites.net
    ```
Az előző parancs is létrehoz egy Git-kompatibilis alkalmazást, amely lehetővé teszi, hogy az üzembe helyezés az Azure-ba, hogy a helyi Git-adattárban. A helyi Git-tárház meg van adva az az URL-címet: https://<username>@< app_name >.scm.azurewebsites.net/ < app_name > .git.

Miután elvégezte az előző parancs, a helyi Git-tárház egy távoli Azure is hozzáadhat. Cserélje le `<url>` a Git-adattár URL-címével.

    ```
    git remote add azure <url>
    ```

## <a name="enable-a-managed-identity-for-the-web-app"></a>Felügyelt identitás engedélyezése a webalkalmazáshoz

Az Azure Key Vault módot kínál a hitelesítő adatok, valamint egyéb kulcsok és titkos kódok biztonságos tárolására, azonban a kódnak hitelesítenie kell magát a Key Vaultban az adatok lekéréséhez. [Az Azure-erőforrások felügyelt identitásainak áttekintése](../active-directory/managed-identities-azure-resources/overview.md) leegyszerűsíti ezt a problémát, mivel az Azure-szolgáltatások számára egy automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD-ben). Ezzel az identitással bármely, az Azure AD-hitelesítést támogató szolgáltatásban, többek között a Key Vaultban is elvégezheti a hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

Futtassa az identitás hozzárendelésére szolgáló parancsot az alkalmazás identitásának létrehozásához:

```azurecli
az webapp identity assign --name <app_name> --resource-group "<YourResourceGroupName>"
```

Ez a parancs egyenértékű azzal, mintha megnyitná a portált, és a webalkalmazás tulajdonságai között átállítaná az **Identitás / Rendszer által hozzárendelt** beállítást **Be** értékűre.

### <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a>Engedélyek kiosztása az alkalmazásnak a Key Vault titkos kulcsainak olvasásához

Jegyezze fel az előző parancs kimenetét. A következő formátumban kell lennie:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ezután futtassa a következő parancsot a kulcstartó nevét és értékét **principalId**:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>A Node-alkalmazás üzembe helyezése az Azure-ba, és a titkos érték lekérése

Futtassa a következő parancsot az alkalmazás telepítése az Azure-bA:

```
git push azure master
```

Ezt követően amikor https://<app_name>.azurewebsites.net, keresse meg a titkos érték látható. Győződjön meg arról, hogy lecseréli-e a neve, <YourKeyVaultName> a tároló nevére.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure SDK a Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
