---
title: Gyors útmutató – a Azure Key Vault titkos kulcsának beállítása és beolvasása a Node Web App használatával | Microsoft Docs
description: Ebben a rövid útmutatóban egy csomópont-webalkalmazás használatával állít be és kér le titkos kulcsot a Azure Key Vaultból
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: quickstart
ms.date: 09/03/2010
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 4bea46e62f90a41b566781457a39718849ee0e15
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70259241"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-by-using-a-node-web-app"></a>Gyors útmutató: Azure Key Vault titkos kulcsának beállítása és beolvasása egy csomópont-webalkalmazás használatával 

Ez a rövid útmutató bemutatja, hogyan tárolhatja a titkos kulcsokat a Azure Key Vaultban, és hogyan kérheti le azt egy webalkalmazás használatával. A Key Vault használata segít megőrizni az adatokat. A titkos érték megjelenítéséhez futtatnia kell ezt a rövid útmutatót az Azure-ban. A rövid útmutató Node.js kódot és Azure-erőforrásokhoz felügyelt-identitásokat alkalmaz. Az alábbiak végrehajtásának módját ismerheti meg:

* Kulcstartó létrehozása.
* Titkos kulcs tárolása a kulcstartóban.
* Titkos kulcs lekérése a kulcstartóból.
* Azure-webalkalmazás létrehozása.
* [Felügyelt identitás](../active-directory/managed-service-identity/overview.md) engedélyezése a webalkalmazáshoz.
* A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a kulcstartóból való olvasásához.

A folytatás előtt győződjön meg arról, hogy már ismeri a [Key Vault alapvető fogalmait](key-vault-whatis.md#basic-concepts).

> [!NOTE]
> A Key Vault egy központi adattár a titkos kulcsok programozott módon való tárolásához. A használatához azonban az alkalmazásoknak és a felhasználóknak először hitelesíteniük kell magukat a Key Vaultban, azaz be kell mutatniuk egy titkos kulcsot. Az ajánlott biztonsági eljárásoknak megfelelően ezt az első titkot rendszeresen el kell forgatni. 
>
> Az Azure- [erőforrások felügyelt szolgáltatásbeli identitásával](../active-directory/managed-identities-azure-resources/overview.md)az Azure-ban futó alkalmazások az Azure által automatikusan felügyelt identitást kapnak. Ez segít megoldani a *titkos kulcsok bemutatásának problémáját*, így a felhasználók és az alkalmazások az ajánlott eljárásokat követhetik, és nem kell aggódniuk az első titkos kulcs leváltása miatt.

## <a name="prerequisites"></a>Előfeltételek

* [Node.js](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) -2.0.4 vagy újabb verzió. Ehhez a rövid útmutatóhoz helyileg kell futtatnia az Azure CLI-t. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](https://review.docs.microsoft.com/en-us/cli/azure/install-azure-cli?branch=master&view=azure-cli-latest).
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
Az alábbi példa egy erőforráscsoportot hoz létre az USA keleti régiójában.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

A cikkben végig az imént létrehozott erőforráscsoportot használjuk majd.

## <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Ezután létrehoz egy kulcstartót az előző lépésben létrehozott erőforráscsoport használatával. Bár ez a cikk a "ContosoKeyVault" nevet használja névként, egyedi nevet kell használnia. Adja meg az alábbi információkat:

* Key Vault neve.
* Az erőforráscsoport neve. A névnek 3-24 karakterből álló sztringnek kell lennie, és csak 0-9, a-z, A-Z és egy kötőjelet (-) tartalmazhat.
* Helyen **USA keleti**régiója.

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

A forrás szerkesztéséhez szükséges helyi másolat létrehozásához klónozza az adattárat. Futtassa a következő parancsot:

```
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Függőségek telepítése

A függőségek telepítéséhez futtassa a következő parancsokat:

```
cd key-vault-node-quickstart
npm install
```

Ez a projekt két Node-modult használ: [MS-Rest-Azure](https://www.npmjs.com/package/ms-rest-azure) és [Azure-](https://www.npmjs.com/package/azure-keyvault)kulcstartó.

## <a name="publish-the-web-app-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Hozzon létre egy [Azure app Service](https://azure.microsoft.com/services/app-service/) tervet. Ebben a csomagban több webalkalmazást is tárolhat.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
Következő lépésként hozzon létre egy webalkalmazást. A következő példában cserélje le `<app_name>` egy globálisan egyedi alkalmazás nevére (az érvényes karakterek: a-z, 0-9 és-). A futtatókörnyezet beállítása: NODE|6.9. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa a parancsot `az webapp list-runtimes`.

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
Keresse meg az újonnan létrehozott webalkalmazást, és ellenőrizze, hogy működik-e. Cserélje le `<app_name>` egy egyedi névre.

    ```
    http://<app name>.azurewebsites.net
    ```
Az előző parancs egy git-kompatibilis alkalmazást is létrehoz, amely lehetővé teszi az Azure-ba való üzembe helyezést a helyi git-tárházból. A helyi git-tárház a következő URL-címmel `https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git`van konfigurálva:.

Az előző parancs befejezése után hozzáadhat egy távoli Azure-t a helyi git-tárházhoz. Cserélje `<url>` le a t a git-tárház URL-címére.

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
        
Ezután futtassa a következő parancsot a Key Vault nevével és a **principalId**értékével:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get set
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>A Node-alkalmazás üzembe helyezése az Azure-ban és a titkos érték beolvasása

Futtassa az alábbi parancsot az alkalmazás Azure-beli üzembe helyezéséhez:

```
git push azure master
```

Ezt követően a Tallózás gombra `https://<app_name>.azurewebsites.net`kattintva megtekintheti a titkos értéket. Győződjön meg arról, hogy a nevet `<YourKeyVaultName>` a tároló nevére cserélte.

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Key Vault, és egy titkos kulcsot tárolt benne. Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, folytassa az alábbi cikkekkel.

- [A Azure Key Vault áttekintése](key-vault-overview.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](key-vault-developers-guide.md)
- Tudnivalók a [kulcsokról, a titkokról és a tanúsítványokról](about-keys-secrets-and-certificates.md)
- [Azure Key Vault ajánlott eljárások](key-vault-best-practices.md) áttekintése