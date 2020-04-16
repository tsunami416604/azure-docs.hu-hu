---
title: Szolgáltatáskatalógus felügyelt alkalmazásának közzététele
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
author: tfitzmac
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: tomfitz
ms.openlocfilehash: 48aaca64949aafecff27c76ad7572b3c2fa44732
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391503"
---
# <a name="quickstart-create-and-publish-a-managed-application-definition"></a>Rövid útmutató: Felügyelt alkalmazásdefiníció létrehozása és közzététele

Ez a rövid útmutató bemutatja az [Azure felügyelt alkalmazásokkal](overview.md)való munkát. Létrehozhat és közzétehet egy felügyelt alkalmazást, amely a szervezet tagjainak szól.

Felügyelt alkalmazás szolgáltatáskatalógusban való közzétételéhez a következőket kell tennie:

* Létre kell hoznia egy sablont, amely meghatározza a felügyelt alkalmazással üzembe helyezendő erőforrásokat.
* Meg kell határoznia a felhasználói felület elemeit a portál számára, amikor üzembe helyezi a felügyelt alkalmazást.
* Létre kell hoznia egy .zip csomagot, amely a szükséges sablonfájlokat tartalmazza.
* El kell döntenie, hogy melyik felhasználónak, csoportnak vagy alkalmazásnak kell hozzáférést biztosítani a felhasználó előfizetésében található erőforráscsoporthoz.
* Létre kell hoznia a felügyelt alkalmazás definícióját, amely a .zip csomagra mutat, és hozzáférést kér az identitás számára.

## <a name="create-the-arm-template"></a>Az ARM sablon létrehozása

Minden felügyelt alkalmazás definíciója tartalmaz egy **mainTemplate.json** nevű fájlt. Ebben lehet meghatározni a telepítendő Azure-erőforrásokat. A sablon nem különbözik a hagyományos Azure Resource Manager (ARM) sablon.

Hozzon létre egy **mainTemplate.json** nevű fájlt. A név megkülönbözteti a kis- és nagybetűket.

Adja hozzá az alábbi JSON-kódot a fájlhoz. Ez a kód határozza meg a tárfiókok létrehozásához szükséges paramétereket, és megadja a tárfiók tulajdonságait.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageAccountNamePrefix": {
            "type": "string"
        },
        "storageAccountType": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageAccountName": "[concat(parameters('storageAccountNamePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2019-06-01').primaryEndpoints.blob]"
        }
    }
}
```

Mentse a mainTemplate.json fájlt.

## <a name="define-your-create-experience"></a>A létrehozási élmény meghatározása

Közzétevőként definiálja a felügyelt alkalmazás létrehozásához a portálélményét. A **createUiDefinition.json** fájl létrehozza a portálfelületet. A [vezérlőelemek](create-uidefinition-elements.md) , például a legördülő listák, a szövegmezők és a jelszómezők segítségével meghatározhatja, hogy a felhasználók hogyan adjanak meg bemenetet az egyes paraméterekhez.

**CreateUiDefinition.json** nevű fájl létrehozása (Ez a név nem tartalmazza a kis- és nagybetűket)

Adja hozzá a következő kezdő JSON-t a fájlhoz, és mentse.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Azure.CreateUIDef",
   "version": "0.1.2-preview",
   "parameters": {
        "basics": [
            {}
        ],
        "steps": [
            {
                "name": "storageConfig",
                "label": "Storage settings",
                "subLabel": {
                    "preValidation": "Configure the infrastructure settings",
                    "postValidation": "Done"
                },
                "bladeTitle": "Storage settings",
                "elements": [
                    {
                        "name": "storageAccounts",
                        "type": "Microsoft.Storage.MultiStorageAccountCombo",
                        "label": {
                            "prefix": "Storage account name prefix",
                            "type": "Storage account type"
                        },
                        "defaultValue": {
                            "type": "Standard_LRS"
                        },
                        "constraints": {
                            "allowedTypes": [
                                "Premium_LRS",
                                "Standard_LRS",
                                "Standard_GRS"
                            ]
                        }
                    }
                ]
            }
        ],
        "outputs": {
            "storageAccountNamePrefix": "[steps('storageConfig').storageAccounts.prefix]",
            "storageAccountType": "[steps('storageConfig').storageAccounts.type]",
            "location": "[location()]"
        }
    }
}
```

További információ: [Első lépések a CreateUiDefinition segítségével](create-uidefinition-overview.md)című témakörben olvashat.

## <a name="package-the-files"></a>A fájlok becsomagolása

Adja hozzá a két fájlt az app.zip nevű .zip fájlhoz. A két fájlnak a .zip fájl gyökérszintjén kell lennie. Ha egy mappába helyezi őket, hibaüzenetet fog kapni a felügyelt alkalmazás definíciójának létrehozásakor, amely szerint nem találhatók a szükséges fájlok.

Töltse fel a csomagot egy elérhető helyre, ahonnan mások használhatják. Meg kell adnia egy egyedi nevet a tárfióknak.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name storageGroup -Location eastus

$storageAccount = New-AzStorageAccount `
  -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzStorageBlobContent `
  -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name storageGroup --location eastus

az storage account create \
    --name mystorageaccount \
    --resource-group storageGroup \
    --location eastus \
    --sku Standard_LRS \
    --kind StorageV2

az storage container create \
    --account-name mystorageaccount \
    --name appcontainer \
    --public-access blob

az storage blob upload \
    --account-name mystorageaccount \
    --container-name appcontainer \
    --name "app.zip" \
    --file "D:\myapplications\app.zip"

```

---

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Azure Active Directory felhasználói csoport vagy -alkalmazás létrehozása

A következő lépés egy felhasználói csoport, felhasználó vagy alkalmazás kiválasztása a vevő erőforrásainak kezeléséhez. Ez az identitás engedélyeket kap a felügyelt erőforráscsoporthoz a kijelölt szerepkörnek megfelelően. A szerepkör bármely beépített szerepköralapú hozzáférés-vezérlési (RBAC) szerepkör lehet, például Tulajdonos vagy Közreműködő. Új Azure Active Directory felhasználói csoport létrehozásához tekintse meg [az Azure Active Directoryban csoportok létrehozását és tagok hozzáadását ismertető](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) cikket.

Az erőforrások felügyeletéhez szüksége lesz a felhasználói csoport objektumazonosítójára. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$groupID=(Get-AzADGroup -DisplayName mygroup).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
groupid=$(az ad group show --group mygroup --query objectId --output tsv)
```

---

### <a name="get-the-role-definition-id"></a>A szerepkördefiníció-azonosító lekérése

Ezután annak a beépített RBAC-szerepkörnek a szerepkördefiníció-azonosítójára van szükség, amelyhez hozzáférést szeretne biztosítani a felhasználó, a felhasználói csoport vagy az alkalmazás számára. Általában a Tulajdonos, a Közreműködő vagy az Olvasó szerepkört használják. Az alábbi parancs bemutatja, hogyan kérheti le a tulajdonos szerepkör szerepkördefiníció-azonosítóját:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$ownerID=(Get-AzRoleDefinition -Name Owner).Id
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
ownerid=$(az role definition list --name Owner --query [].name --output tsv)
```

---

### <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Ha még nem rendelkezik erőforráscsoporttal a felügyelt alkalmazás definíciójának tárolásához, hozzon létre egyet:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name appDefinitionGroup -Location westcentralus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name appDefinitionGroup --location westcentralus
```

---

Most hozza létre a felügyelt alkalmazás definíciójához tartozó erőforrást.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
$blob = Get-AzStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
blob=$(az storage blob url --account-name mystorageaccount --container-name appcontainer --name app.zip --output tsv)

az managedapp definition create \
  --name "ManagedStorage" \
  --location "westcentralus" \
  --resource-group appDefinitionGroup \
  --lock-level ReadOnly \
  --display-name "Managed Storage Account" \
  --description "Managed Azure Storage Account" \
  --authorizations "$groupid:$ownerid" \
  --package-file-uri "$blob"
```

---

A parancs befejeződésekor egy felügyeltalkalmazás-definíció található az erőforráscsoportban.

Íme néhány az előző példában használt paraméterek közül:

* **erőforráscsoport**: Annak az erőforráscsoportnak a neve, amelyen a felügyelt alkalmazásdefiníció létrejön.
* **zárolási szint**: A kezelt erőforráscsoportra helyezett zárolás típusa. Megakadályozza, hogy az ügyfél nemkívánatos műveleteket hajtson végre ezen az erőforráscsoporton. Jelenleg a ReadOnly az egyetlen támogatott zárolási szint. Amikor a ReadOnly van megadva, az ügyfél csak olvashatja a felügyelt erőforráscsoportban lévő erőforrásokat. A kezelt erőforráscsoporthoz hozzáféréssel rendelkező közzétevői identitások mentesítve vannak a zárolás alól.
* **authorizations**: A felügyelt erőforráscsoporthoz való engedély biztosításához használt résztvevő-azonosítót és szerepkördefiníció-azonosítót ismerteti. `<principalId>:<roleDefinitionId>` formátumban van megadva. Ha egynél több érték szükséges, adja meg azokat a következő formátumban: `<principalId1>:<roleDefinitionId1> <principalId2>:<roleDefinitionId2>`. Az értékeket szóközzel kell elválasztani egymástól.
* **csomagfájl URI-ja**: A szükséges fájlokat tartalmazó .zip csomag helye.

## <a name="bring-your-own-storage-for-the-managed-application-definition"></a>Saját tárhely használata a felügyelt alkalmazásdefinícióhoz

Dönthet úgy, hogy a felügyelt alkalmazásdefiníciót az Ön által a létrehozás során biztosított tárfiókban tárolja, hogy a helyét és a hozzáférést teljes körűen fel tudja-e-e dolgozni a szabályozási igényeknek megfelelően.

> [!NOTE]
> Hozza a saját tároló csak a felügyelt alkalmazásdefiníció ARM-sablon vagy REST API-központi telepítései által támogatott.

### <a name="select-your-storage-account"></a>Válassza ki tárfiókját

Létre kell [hoznia egy tárfiókot,](../../storage/common/storage-account-create.md) hogy tartalmazza a felügyelt alkalmazás definícióját a Szolgáltatáskatalógus használatával való használatra.

Másolja a tárfiók erőforrás-azonosítóját. A definíció telepítésekor később lesz használva.

### <a name="set-the-role-assignment-for-appliance-resource-provider-in-your-storage-account"></a>A "Appliance Resource Provider" szerepkör-hozzárendelésének beállítása a tárfiókban

Mielőtt a felügyelt alkalmazásdefiníció telepíthető a tárfiókba, meg kell adnia a közreműködői engedélyeket a **készülék erőforrás-szolgáltató** szerepkörhöz, hogy a definíciós fájlokat a tárfiók tárolóba írhassa.

1. Az [Azure Portalon](https://portal.azure.com)keresse meg a tárfiókot.
1. Válassza a **Hozzáférés-vezérlés (IAM) lehetőséget** a tárfiók hozzáférés-vezérlési beállításainak megjelenítéséhez. A **szerepkör-hozzárendelések** listájának megtekintéséhez válassza a Szerepkör-hozzárendelések lapot.
1. A **Szerepkör-hozzárendelés hozzáadása** ablakban válassza a **Közreműködői** szerepkört. 
1. A **Hozzáférés hozzárendelése a** mezőben válassza az **Azure AD felhasználó, csoport vagy egyszerű szolgáltatás lehetőséget.**
1. A **Kijelölés csoportban**keresse meg **a Készülékerőforrás-szolgáltató** szerepkört, és jelölje ki.
1. Mentse a szerepkör-hozzárendelést.

### <a name="deploy-the-managed-application-definition-with-an-arm-template"></a>A felügyelt alkalmazásdefiníció üzembe helyezése ARM sablonnal 

A következő ARM-sablon segítségével telepítheti a csomagolt felügyelt alkalmazást új felügyelt alkalmazásdefinícióként a Service Catalog szolgáltatásban, amelynek definíciós fájljait a saját tárfiókjában tárolják és karbantartják:
   
```json
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "applicationName": {
            "type": "string",
            "metadata": {
                "description": "Managed Application name"
            }
        },
        "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
        "definitionStorageResourceID": {
            "type": "string",
            "metadata": {
                "description": "Storage account resource ID for where you're storing your definition"
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located."
            }
        }
    },
    "variables": {
        "lockLevel": "None",
        "description": "Sample Managed application definition",
        "displayName": "Sample Managed application definition",
        "managedApplicationDefinitionName": "[parameters('applicationName')]",
        "packageFileUri": "[parameters('_artifactsLocation')]",
        "defLocation": "[parameters('definitionStorageResourceID')]",
        "managedResourceGroupId": "[concat(subscription().id,'/resourceGroups/', concat(parameters('applicationName'),'_managed'))]",
        "applicationDefinitionResourceId": "[resourceId('Microsoft.Solutions/applicationDefinitions',variables('managedApplicationDefinitionName'))]"
    },
    "resources": [
        {
            "type": "Microsoft.Solutions/applicationDefinitions",
            "apiVersion": "2019-07-01",
            "name": "[variables('managedApplicationDefinitionName')]",
            "location": "[parameters('location')]",
            "properties": {
                "lockLevel": "[variables('lockLevel')]",
                "description": "[variables('description')]",
                "displayName": "[variables('displayName')]",
                "packageFileUri": "[variables('packageFileUri')]",
                "storageAccountId": "[variables('defLocation')]"
            }
        }
    ],
    "outputs": {}
}
```

Hozzáadtunk egy **új, storageAccountId** nevű tulajdonságot az alkalmazáshozDefintion tulajdonságaihoz, és megadtuk a tárfiók azonosítóját, amelyben a definíciót az értékként szeretné tárolni:

Ellenőrizheti, hogy az alkalmazásdefiníciós fájlok a megadott tárfiókba vannak-e mentve egy **alkalmazásdefiníciós**tárolóba.

> [!NOTE]
> A nagyobb biztonság érdekében létrehozhat egy felügyelt alkalmazásdefiníciót, amely egy [Azure-tárfiók blobjában](../../storage/common/storage-service-encryption.md)tárolja, ahol engedélyezve van a titkosítás. A definíció tartalma a tárfiók titkosítási beállításaival vannak titkosítva. Csak a fájlhoz engedéllyel rendelkező felhasználók láthatják a definíciót a Szolgáltatáskatalógusban.

## <a name="make-sure-users-can-see-your-definition"></a>Győződjön meg arról, hogy a definíció a felhasználók számára látható

Ön hozzáfér a felügyelt alkalmazás definíciójához, azonban gondoskodnia kell róla, hogy a vállalat más felhasználói is hozzáférjenek. Engedélyezzen számukra legalább Olvasó szerepkört a definícióhoz. Előfordulhat, hogy ezt a hozzáférési szintet már megörökölték az előfizetéstől vagy az erőforráscsoporttól. A definíció elérésének ellenőrzésével, valamint további felhasználók és csoportok hozzáadásával kapcsolatban lásd: [Az Azure-előfizetések erőforrásaihoz való hozzáférés kezelése szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>További lépések

Sikeresen közzétette a felügyelt alkalmazás definícióját. Most megismerheti, hogyan helyezheti üzembe a definíció egyik példányát.

> [!div class="nextstepaction"]
> [Rövid útmutató: Szolgáltatáskatalógusban elérhető alkalmazás telepítése](deploy-service-catalog-quickstart.md)
