---
title: "Azure-tároló példányát az Azure fájlok kötet csatlakoztatása"
description: "Útmutató: Azure tároló osztályt állapot megőrizni az Azure fájlok kötet csatlakoztatása"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/31/2017
ms.author: seanmck
ms.custom: mvc
ms.openlocfilehash: 41c3a449b39d6ef77e1dd0cf10699f8debcad475
ms.sourcegitcommit: 54fd091c82a71fbc663b2220b27bc0b691a39b5b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2017
---
# <a name="mounting-an-azure-file-share-with-azure-container-instances"></a>Azure-tároló osztályt az Azure fájlmegosztások csatlakoztatása

Alapértelmezés szerint Azure tároló példányok állapot nélküli alkalmazások. Ha a tároló összeomlik, vagy leállítja, annak teljes állapota elvész. Állapot élettartama meghaladja a tároló megőrizni, a kötet csatlakoztatnia kell külső áruházban. Ez a cikk bemutatja, hogyan használható Azure tároló osztályt egy Azure fájlmegosztás csatlakoztatásához.

## <a name="create-an-azure-file-share"></a>Az Azure-fájlmegosztás létrehozása

Az Azure fájlmegosztások használatához Azure tároló osztályt, akkor létre kell hoznia. A következő parancsprogrammal hozzon létre egy tárfiókot, a fájlmegosztás és a megosztás saját magát. A tárfiók nevének globálisan egyedi, kell lennie, a parancsfájl egy véletlenszerű értékét hozzáadja a kezdőpontját meghatározó karakterlánc.

```azurecli-interactive
# Change these four parameters
ACI_PERS_STORAGE_ACCOUNT_NAME=mystorageaccount$RANDOM
ACI_PERS_RESOURCE_GROUP=myResourceGroup
ACI_PERS_LOCATION=eastus
ACI_PERS_SHARE_NAME=acishare

# Create the storage account with the parameters
az storage account create -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -l $ACI_PERS_LOCATION --sku Standard_LRS

# Export the connection string as an environment variable, this is used when creating the Azure file share
export AZURE_STORAGE_CONNECTION_STRING=`az storage account show-connection-string -n $ACI_PERS_STORAGE_ACCOUNT_NAME -g $ACI_PERS_RESOURCE_GROUP -o tsv`

# Create the share
az storage share create -n $ACI_PERS_SHARE_NAME
```

## <a name="acquire-storage-account-access-details"></a>Szerezzen be tárfiókadatok hozzáférés

Az Azure fájlmegosztások csatlakoztatása kötetként az Azure-tároló példányok, három értékek kell: a tárfiók nevét, a megosztás neve és a hozzáférési kulcsot.

Ha követte a fenti parancsfájl, a tárfiók nevének végén véletlenszerű értéket hozták létre. A végső karakterláncban (beleértve a véletlenszerű része) lekérdezéséhez használja a következő parancsokat:

```azurecli-interactive
STORAGE_ACCOUNT=$(az storage account list --resource-group $ACI_PERS_RESOURCE_GROUP --query "[?contains(name,'$ACI_PERS_STORAGE_ACCOUNT_NAME')].[name]" -o tsv)
echo $STORAGE_ACCOUNT
```

A megosztásnév már ismert (Ez *acishare* a parancsfájlban), hogy most már a tárfiók hívóbetűjét, amely a következő paranccsal található összes:

```azurecli-interactive
STORAGE_KEY=$(az storage account keys list --resource-group $ACI_PERS_RESOURCE_GROUP --account-name $STORAGE_ACCOUNT --query "[0].value" -o tsv)
echo $STORAGE_KEY
```

## <a name="store-storage-account-access-details-with-azure-key-vault"></a>Hozzáférés tárfiókadatok együtt az Azure key vault tárolásához

Tárfiókkulcsok védi a adatokhoz való hozzáférés, ezért javasoljuk, hogy az az Azure key vault tárolja őket.

Kulcstároló létrehozása az Azure parancssori felület:

```azurecli-interactive
KEYVAULT_NAME=aci-keyvault
az keyvault create -n $KEYVAULT_NAME --enabled-for-template-deployment -g $ACI_PERS_RESOURCE_GROUP
```

A `enabled-for-template-deployment` kapcsoló nyújt a Azure Resource Manager lekéréses titkokat a kulcstartót a központi telepítéskor.

A tárfiók hívóbetűjét, az új titkos kulcsot a key vaultban. tároló:

```azurecli-interactive
KEYVAULT_SECRET_NAME=azurefilesstoragekey
az keyvault secret set --vault-name $KEYVAULT_NAME --name $KEYVAULT_SECRET_NAME --value $STORAGE_KEY
```

## <a name="mount-the-volume"></a>A kötet csatlakoztatása

Az Azure fájlmegosztások csatlakoztatása a tárolóban lévő kötetként két lépésből áll. Először akkor adja meg a megosztást a tárolócsoport definiálása adatait, majd egy vagy több csoport tárolók csatlakoztatott kötet módjának megadása.

A kötetek csatlakoztatása az elérhetővé tenni kívánt megadásához adja hozzá a `volumes` a tömb a tároló meghatározása az Azure Resource Manager sablon, majd hivatkozhat őket az egyes tárolókban definíciójában.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "type": "string"
    },
    "storageaccountkey": {
      "type": "securestring"
    }
  },
  "resources":[{
    "name": "hellofiles",
    "type": "Microsoft.ContainerInstance/containerGroups",
    "apiVersion": "2017-08-01-preview",
    "location": "[resourceGroup().location]",
    "properties": {
      "containers": [{
        "name": "hellofiles",
        "properties": {
          "image": "seanmckenna/aci-hellofiles",
          "resources": {
            "requests": {
              "cpu": 1,
              "memoryInGb": 1.5
            }
          },
          "ports": [{
            "port": 80
          }],
          "volumeMounts": [{
            "name": "myvolume",
            "mountPath": "/aci/logs/"
          }]
        }
      }],
      "osType": "Linux",
      "ipAddress": {
        "type": "Public",
        "ports": [{
          "protocol": "tcp",
          "port": "80"
        }]
      },
      "volumes": [{
        "name": "myvolume",
        "azureFile": {
          "shareName": "acishare",
          "storageAccountName": "[parameters('storageaccountname')]",
          "storageAccountKey": "[parameters('storageaccountkey')]"
        }
      }]
    }
  }]
}
```

A sablon tartalmazza a tárfiók nevét és a kulcs biztosítható, hogy egy külön paraméterek fájlban paraméterekként. A paraméterek fájl feltöltéséhez, három értékeket kell: a tárfiók nevét, az erőforrás-azonosítója a az Azure key vault, és a titkos kulcstároló neve, a kulcs tárolására használt. Ha már elvégezte az előző lépéseket, ezeket az értékeket a következő parancsfájl kaphat:

```azurecli-interactive
echo $STORAGE_ACCOUNT
echo $KEYVAULT_SECRET_NAME
az keyvault show --name $KEYVAULT_NAME --query [id] -o tsv
```

Szúrja be az értékeket a paraméterek fájlba:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageaccountname": {
      "value": "<my_storage_account_name>"
    },
   "storageaccountkey": {
      "reference": {
        "keyVault": {
          "id": "<my_keyvault_id>"
        },
        "secretName": "<my_storage_account_key_secret_name>"
      }
    }
  }
}
```

## <a name="deploy-the-container-and-manage-files"></a>A tároló üzembe és kezelhet fájlokat

A sablon definiált a tároló létrehozása, és csatlakoztassa a kötetet, az Azure parancssori felület használatával. Feltételezve, hogy a sablon fájl neve *azuredeploy.json* és a paraméterfájlban nevű *azuredeploy.parameters.json*, akkor a parancssorban:

```azurecli-interactive
az group deployment create --name hellofilesdeployment --template-file azuredeploy.json --parameters @azuredeploy.parameters.json --resource-group myResourceGroup
```

A tároló elindul, ha a egyszerű webalkalmazást telepített keresztül is használhatja a **seanmckenna/aci-hellofiles** lemezképet, az Azure-fájlmegosztáshoz megadott csatlakoztatási elérési úton lévő fájlok kezeléséhez. Az IP-cím a következő webalkalmazás az beszerzése:

```azurecli-interactive
az container show --resource-group myResourceGroup --name hellofiles -o table
```

Egy eszköz, például használhatja a [Microsoft Azure Tártallózó](http://storageexplorer.com) kérhető le, és vizsgálja meg a fájlmegosztás írni a fájlt.

>[!NOTE]
> Azure Resource Manager-sablonok használatával kapcsolatos további tudnivalókért alkalmazásparaméter-fájlokat, és az Azure parancssori Felülettel történő telepítése, lásd: [erőforrások a Resource Manager-sablonok és az Azure parancssori felület telepítése](../azure-resource-manager/resource-group-template-deploy-cli.md).

## <a name="next-steps"></a>Következő lépések

- Az első tároló használata az Azure-tároló példányok telepítése [gyors üzembe helyezés](container-instances-quickstart.md)
- További tudnivalók a [Azure tároló példányok és tároló orchestrators közötti kapcsolat](container-instances-orchestrator-relationship.md)
