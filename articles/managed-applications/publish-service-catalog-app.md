---
title: Hozzon létre, és az Azure szolgáltatás katalógus által felügyelt alkalmazások közzététele |} Microsoft Docs
description: Bemutatja, hogyan hozható létre egy, a szervezete tagjainak szánt Azure-beli felügyelt alkalmazás.
services: managed-applications
author: tfitzmac
manager: timlt
ms.service: managed-applications
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/15/2018
ms.author: tomfitz
ms.openlocfilehash: 57821e9c7ed1ca04aa7442f089268c5e89a017c3
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="publish-a-managed-application-for-internal-consumption"></a>A belső felhasználásához kezelt alkalmazás közzététele

Létrehozhat és közzététele az Azure [kezelt alkalmazások](overview.md) szolgálnak, hogy a szervezet tagjaira. Például hogy az informatikai részleg, amelyek biztosítják a vállalati szabványoknak való megfelelés felügyelt alkalmazások közzététele. A kezelt alkalmazások a szolgáltatáskatalógus, nem az Azure piactéren keresztül érhetők el.

A szolgáltatáskatalógus a kezelt alkalmazás közzététele a következőket kell tennie:

* Hozzon létre egy sablont, amely meghatározza a kezelt alkalmazással üzembe helyezendő erőforrásokat.
* Adja meg a felhasználói felület elemei a portálhoz, a kezelt alkalmazás telepítésekor.
* A szükséges sablont fájlokat tartalmazó .zip-csomag létrehozása.
* Döntse el, mely felhasználó, csoport vagy alkalmazás hozzá kell férnie az erőforráscsoport, a felhasználó az előfizetéshez.
* Hozzon létre a kezelt alkalmazás-definíciót, amely a .zip-csomagja mutat, és az identitás hozzáférést kér.

Ebben a cikkben a felügyelt alkalmazás tartalmazza a storage-fiók. Célja, hogy a kezelt alkalmazás közzétételének lépéseket mutatja be. Teljes példákért lásd [a kezelt alkalmazások az Azure-hoz](sample-projects.md).

## <a name="create-the-resource-template"></a>Az erőforrás-sablon létrehozása

Minden felügyelt definíciót tartalmaz egy nevű fájlt **mainTemplate.json**. Az oktatóanyagban az Azure-erőforrások kiépítését meghatározása. A sablon pont olyan, mint egy átlagos Resource Manager-sablon.

Hozzon létre egy fájlt **mainTemplate.json**. A név nem kis-és nagybetűket.

Adja hozzá a fájlhoz a következő JSON. A storage-fiók létrehozásához a paramétereket, és határozza meg a tárfiók tulajdonságait.

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
            "name": "[variables('storageAccountName')]",
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('storageAccountType')]"
            },
            "kind": "Storage",
            "properties": {}
        }
    ],
    "outputs": {
        "storageEndpoint": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2016-01-01').primaryEndpoints.blob]"
        }
    }
}
```

Mentse a mainTemplate.json fájlt.

## <a name="create-the-user-interface-definition"></a>A felhasználói felület definíció létrehozása

Az Azure-portálon használja a **createUiDefinition.json** fájlt létrehozni a felhasználói felület, a felhasználók számára a kezelt alkalmazás létrehozása. Azt határozza meg, hogyan felhasználói adatbevitelt mindegyik paraméterhez. Beállítások is használhat, például a legördülő listából válassza ki, szövegmezőben, jelszó mezőbe, és más beviteli eszközök. Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.

Hozzon létre egy fájlt **createUiDefinition.json**. A név nem kis-és nagybetűket.

Adja hozzá a fájlhoz a következő JSON.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Compute.MultiVm",
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

Mentse a createUiDefinition.json fájlt.

## <a name="package-the-files"></a>A csomagfájlok

A két fájlokat adjon hozzá egy .zip fájlt app.zip. A két fájlt kell a gyökérszinten .zip fájl. Ha azokat egy mappába helyezett, hibaüzenet arról, hogy a szükséges fájlok hiányoznak a kezelt alkalmazás definíciójának létrehozásakor. 

Töltse fel a csomag elérhető helyen a ahol képes használni. 

```powershell
New-AzureRmResourceGroup -Name storageGroup -Location eastus
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName storageGroup `
  -Name "mystorageaccount" `
  -Location eastus `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context

New-AzureStorageContainer -Name appcontainer -Context $ctx -Permission blob

Set-AzureStorageBlobContent -File "D:\myapplications\app.zip" `
  -Container appcontainer `
  -Blob "app.zip" `
  -Context $ctx 
```

## <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

### <a name="create-an-azure-active-directory-user-group-or-application"></a>Egy Azure Active Directory felhasználói csoport vagy az alkalmazás létrehozása

A következő lépés, hogy egy felhasználói csoport vagy az erőforrások kezelése az ügyfél nevében az alkalmazás kiválasztása. A felhasználói csoport vagy az alkalmazás engedélyekkel rendelkezzen a felügyelt erőforráscsoporthoz rendelt szerepkör alapján. A szerepkör lehet minden olyan beépített szerepköralapú hozzáférés-vezérlést (RBAC) szerepkört, például a tulajdonos vagy közreműködő szerepkörrel. Is egy adott felhasználó engedélyt adhat a erőforrások kezeléséhez, de általában ez az engedély hozzárendelése egy felhasználói csoportot. Hozzon létre egy új Active Directory-felhasználócsoportot, lásd: [hozzon létre egy csoportot, és tagokat vehet az Azure Active Directoryban](../active-directory/active-directory-groups-create-azure-portal.md).

Az Objektumazonosító, a felhasználói csoport számára az erőforrások kezelése van szüksége. 

```powershell
$groupID=(Get-AzureRmADGroup -DisplayName mygroup).Id
```

### <a name="get-the-role-definition-id"></a>A szerepkör-definíció azonosítója beolvasása

A következő lépésben azt szeretné, hogy hozzáférést biztosítson a felhasználó, a felhasználói csoport vagy az alkalmazás RBAC beépített szerepkör szerepkör-definíció azonosítója. Általában akkor használják a tulajdonos vagy közreműködő vagy olvasó szerepkört. Az alábbi parancs bemutatja, hogyan kérheti le a tulajdonos szerepkör szerepkördefiníció-azonosítóját:

```powershell
$ownerID=(Get-AzureRmRoleDefinition -Name Owner).Id
```

### <a name="create-the-managed-application-definition"></a>A felügyelt alkalmazás definíciójának létrehozása

Ha még nem rendelkezik egy erőforráscsoportot a kezelt alkalmazás definícióját tárolásához, hozzon létre egyet:

```powershell
New-AzureRmResourceGroup -Name appDefinitionGroup -Location westcentralus
```

Most hozza létre a felügyelt alkalmazás definíciójához tartozó erőforrást.

```powershell
$blob = Get-AzureStorageBlob -Container appcontainer -Blob app.zip -Context $ctx

New-AzureRmManagedApplicationDefinition `
  -Name "ManagedStorage" `
  -Location "westcentralus" `
  -ResourceGroupName appDefinitionGroup `
  -LockLevel ReadOnly `
  -DisplayName "Managed Storage Account" `
  -Description "Managed Azure Storage Account" `
  -Authorization "${groupID}:$ownerID" `
  -PackageFileUri $blob.ICloudBlob.StorageUri.PrimaryUri.AbsoluteUri
```

## <a name="create-the-managed-application"></a>A kezelt alkalmazás létrehozása

A kezelt alkalmazás, a portál, a PowerShell vagy az Azure parancssori felület telepítése.

### <a name="powershell"></a>PowerShell

Először hozzuk a PowerShell használatával történő telepítése a kezelt alkalmazás.

```powershell
# Create resource group
New-AzureRmResourceGroup -Name applicationGroup -Location westcentralus

# Get ID of managed application definition
$appid=(Get-AzureRmManagedApplicationDefinition -ResourceGroupName appDefinitionGroup -Name ManagedStorage).ManagedApplicationDefinitionId

# Create the managed application
New-AzureRmManagedApplication `
  -Name storageApp `
  -Location westcentralus `
  -Kind ServiceCatalog `
  -ResourceGroupName applicationGroup `
  -ManagedApplicationDefinitionId $appid `
  -ManagedResourceGroupName "InfrastructureGroup" `
  -Parameter "{`"storageAccountNamePrefix`": {`"value`": `"demostorage`"}, `"storageAccountType`": {`"value`": `"Standard_LRS`"}}"
```

A kezelt alkalmazás és a felügyelt infrastruktúra most már szerepel az előfizetés.

### <a name="portal"></a>Portál

Most tegyük a kezelt alkalmazás központi telepítése a portál használatával. A felhasználói felület, a csomag létrehozott láthatja.

1. Ugrás az Azure-portálon. Válassza ki **+ hozzon létre egy erőforrást** keresse meg a **szolgáltatáskatalógus**.

   ![Keresési szolgáltatáskatalógus](./media/publish-service-catalog-app/create-new.png)

1. Válassza ki **szolgáltatáskatalógus felügyelt alkalmazás**.

   ![Válassza ki a szolgáltatáskatalógus](./media/publish-service-catalog-app/select-service-catalog-managed-app.png)

1. Kattintson a **Létrehozás** gombra.

   ![Válassza ki létrehozása](./media/publish-service-catalog-app/select-create.png)

1. A kezelt alkalmazás, a rendelkezésre álló megoldások listájából létrehozásához keresse meg és válassza ki azt. Kattintson a **Létrehozás** gombra.

   ![Kezelt alkalmazás megkeresése](./media/publish-service-catalog-app/find-application.png)

1. Adja meg a kezelt alkalmazás szükséges alapvető információkat. Adja meg az előfizetés és a kezelt alkalmazás tartalmaz egy új erőforráscsoportot. Válassza ki **nyugati középső Régiójában** helyéhez. Ha elkészült, kattintson az **OK** gombra.

   ![Adja meg a felügyelt alkalmazási paraméterek](./media/publish-service-catalog-app/add-basics.png)

1. Adja meg az erőforrásokat a felügyelt jellemző. Ha elkészült, kattintson az **OK** gombra.

   ![Adja meg az erőforrás-paraméterek](./media/publish-service-catalog-app/add-storage-settings.png)

1. A sablon ellenőrzi a megadott értékeket. Ha az érvényesítés sikeres, válassza ki a **OK** a telepítés elindításához.

   ![A felügyelt alkalmazási ellenőrzése](./media/publish-service-catalog-app/view-summary.png)

A telepítés befejezése után a kezelt alkalmazás szerepel alkalmazáscsoport nevű erőforráscsoport. A tárfiók egy erőforráscsoportot alkalmazáscsoport és kivonatolt karakterlánc-érték szerepel.

## <a name="next-steps"></a>További lépések

* A felügyelt alkalmazások bemutatásáért tekintse meg a [felügyelt alkalmazások áttekintését](overview.md).
* Például a projekteket, lásd: [a kezelt alkalmazások az Azure-hoz](sample-projects.md).
* Felhasználóifelület-definíciós fájl felügyelt alkalmazáshoz való létrehozásával kapcsolatban tekintse meg a [CreateUiDefinition első lépéseit bemutató](create-uidefinition-overview.md) témakört.
