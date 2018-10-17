---
title: Rövid útmutató – Titkos kulcs beállítása és lekérése az Azure Key Vaultból Node-webalkalmazás használatával | Microsoft Docs
description: Rövid útmutató – Titkos kulcs beállítása és lekérése az Azure Key Vaultból Node-webalkalmazás használatával
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
ms.openlocfilehash: 860294ebc7fbadd3eeefc4298ec740ca7f704587
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714394"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-a-node-web-app"></a>Rövid útmutató: Titkos kulcs beállítása és lekérése az Azure Key Vaultból Node-webalkalmazás használatával 

Ez a rövid útmutató azt mutatja be, hogyan lehet tárolni a titkos kulcsokat a Key Vaultban, és hogyan lehet őket lekérni egy webalkalmazással. Ahhoz, hogy láthassa a titkos kulcs értékét, az Azure-ban kell dolgoznia. A rövid útmutató Node.js kódot és Azure-erőforrásokhoz felügyelt-identitásokat alkalmaz.

> [!div class="checklist"]
> * Key Vault létrehozása.
> * Titkos kulcs tárolása a Key Vaultban.
> * Titkos kulcs lekérése a Key Vaultból.
> * Azure-webalkalmazás létrehozása.
> * [Felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) engedélyezése a webalkalmazáshoz.
> * A szükséges engedélyek megadása a webalkalmazás számára az adatoknak a Key Vaultból való olvasásához.

Mielőtt folytatná, győződjön meg arról, hogy tisztában van az [alapvető fogalmakkal](key-vault-whatis.md#basic-concepts).

>[!NOTE]
Ahhoz, hogy megérthesse, miért az alábbi oktatóanyagban ismertetett folyamat az ajánlott eljárás, néhány fogalommal tisztában kell lennie. A Key Vault egy központi adattár a titkos kulcsok programozott módon való tárolásához. A használatához azonban az alkalmazásoknak/felhasználóknak először hitelesíteniük kell magukat a Key Vaultban, azaz be kell mutatniuk egy titkos kulcsot. Az ajánlott biztonsági eljárások betartása érdekében ezt az első titkos kulcsot rendszeres időközönként le kell váltani. Az Azure-ban futó [Felügyelt identitások Azure erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md)-alkalmazásokhoz jár egy olyan identitás, amelyet az Azure automatikusan felügyel. Ez segít megoldani a **titkos kulcsok bemutatásának problémáját**, mivel a felhasználók/alkalmazások követhetik az ajánlott eljárásokat, és nem kell aggódniuk az első titkos kulcs leváltása miatt.

## <a name="prerequisites"></a>Előfeltételek

* [Node JS](https://nodejs.org/en/)
* [Git](https://www.git-scm.com/)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 vagy újabb verzió
* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="login-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure-ba a parancssori felület használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group#az-group-create) paranccsal. Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat.

Válasszon egy erőforráscsoport-nevet, és töltse ki a helyőrzőt.
A következő példában létrehozunk egy *<YourResourceGroupName>* nevű erőforráscsoportot az *eastus* helyen.

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

Az oktatóanyag az imént létrehozott erőforráscsoport használja.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása;

A következő lépésben létre fog hozni egy Key Vaultot az előző lépésben létrehozott erőforráscsoport használatával. Jóllehet ebben a cikkben a Key Vault neve „ContosoKeyVault”, egyedi nevet kell használnia. Adja meg az alábbi információkat:

* Tároló neve: **Itt válasszon kulcstárolónevet**.
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
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

## <a name="install-dependencies"></a>Függőségek telepítése

Itt a függőségeket telepítjük. Futtassa a következő parancsokat: cd key-vault-node-quickstart npm install

Ez a projekt 2 csomópontmodult használt:

* [ms-rest-azure](https://www.npmjs.com/package/ms-rest-azure) 
* [azure-keyvault](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a>A webalkalmazás közzététele az Azure-ban

Az alábbiak ismertetik azt a néhány lépést, amelyet végre kell hajtanunk

- Az első lépés egy [Azure App Service](https://azure.microsoft.com/services/app-service/)-csomag létrehozása. Ebben a csomagban több webalkalmazást is tárolhat.

    ```
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
- Ezután létrehozunk egy webalkalmazást. A következő példában cserélje ki az <app_name> helyőrzőt egy globálisan egyedi névre (érvényes karakterek: a–z, 0–9 és -). A futtatókörnyezet beállítása: NODE|6.9. Az összes támogatott futtatókörnyezet megtekintéséhez futtassa az az webapp list-runtimes parancsot.
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
    Tallózással keresse meg az újonnan létrehozott webalkalmazást, és láthatja, hogy az működik. Az <app_name> helyőrző helyett adjon meg egy egyedi nevet.

    ```
    http://<app name>.azurewebsites.net
    ```
    A fenti parancs egy Git-kompatibilis alkalmazást is létrehoz, amely lehetővé teszi az Azure-ba történő üzembe helyezést a helyi Git-adattárból. 
    A helyi Git-adattár a következő URL-címmel van konfigurálva: „https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git”

- Üzembe helyező felhasználó létrehozása Az előző parancs lefutását követően hozzáadhat egy távoli Azure-mappát a helyi Git-adattárhoz. Cserélje le a <url> részt a távoli Git-elem URL-címére, amelyet a Git az alkalmazáson való engedélyezése során kapott meg.

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

Jegyezze fel vagy másolja ki a fenti parancs kimenetét. A következő formátumban kell lennie:
        
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
        
Ezután futtassa ezt a parancsot a kulcstárolója nevének és a fentről másolt PrincipalId értékének használatával:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a>A Node-alkalmazás üzembe helyezése az Azure-ban és a titkos kulcs értékének lekérése

Most már minden készen áll. Futtassa az alábbi parancsot az alkalmazás üzembe helyezéséhez az Azure-ban.

```
git push azure master
```

Ezután a https://<app_name>.azurewebsites.net helyre lépve már láthatja a titkos kód értékét.
Ne feledje a(z) <YourKeyVaultName> helyőrzőt lecserélni a tároló nevére.

## <a name="next-steps"></a>További lépések

* [Az Azure Key Vault kezdőlapja](https://azure.microsoft.com/services/key-vault/)
* [Az Azure Key Vault dokumentációja](https://docs.microsoft.com/azure/key-vault/)
* [Azure SDK For Node](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* [Azure REST API-referencia](https://docs.microsoft.com/rest/api/keyvault/)
